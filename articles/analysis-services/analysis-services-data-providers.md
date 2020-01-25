---
title: Bibliotecas de clientes dos Serviços de Análise Azure / Microsoft Docs
description: Descreve bibliotecas de clientes necessárias para aplicações e ferramentas do cliente para ligar serviços de análise do Azure
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 006e1fb3fcef1ebcd0e09dffb79149e75eec622a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713168"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de clientes para ligação aos Serviços de Análise Azure

As bibliotecas dos clientes são necessárias para que as aplicações e ferramentas do cliente se conectem aos servidores dos Serviços de Análise. Aplicações de clientes da Microsoft como Power BI Desktop, Excel, SQL Server Management Studio (SSMS) e análise de projetos de projetos para O Estúdio Visual instalam as três bibliotecas de clientes e atualizam-nas juntamente com atualizações regulares de aplicações. Em alguns casos, poderá ter de instalar versões mais recentes das bibliotecas de clientes. Aplicações personalizadas do cliente também requerem a instalação de bibliotecas de clientes.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Descarregue as mais recentes bibliotecas de clientes (Instalador do Windows)  

|Transferir  |Versão do produto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.21.27    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.21.27       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.0.5    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.0.5     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacotes NuGet)

Os Objetos de Gestão de Serviços de Análise (AMO) e bibliotecas de clientes ADOMD estão disponíveis como pacotes instalados a partir de [NuGet.org](https://www.nuget.org/). Recomenda-se que emigra para referências NuGet em vez de utilizar o Instalador do Windows. 

|Pacote  | Versão do produto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.0.5     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.0.5      |

Conjuntos de pacotes NuGet AssemblyVersion siga a versão semântica: MAJOR. MENOR. UM REMENDO. As referências NuGet carregam a versão esperada mesmo que exista uma versão diferente no GAC (resultante da instalação de MSI). O PATCH é incrementado para cada lançamento. As versões AMO e ADOMD são mantidas em sincronização.

## <a name="understanding-client-libraries"></a>Compreender bibliotecas de clientes

Os Serviços de Análise utilizam três bibliotecas de clientes, também conhecidos como fornecedores de dados. ADOMD.NET e Análise De Serviços de Gestão de Objetos (AMO) são bibliotecas de clientes geridas. O Provedor de Serviços de Análise OLE DB (MSOLAP DLL) é uma biblioteca de clientes nativos. Normalmente, os três estão instalados ao mesmo tempo. **Os Serviços de Análise Azure requerem as versões mais recentes das três bibliotecas.** 

Aplicações de clientes da Microsoft como Power BI Desktop e Excel instalam as três bibliotecas de clientes e atualizam-nas quando novas versões estiverem disponíveis. Dependendo da versão ou frequência das atualizações, algumas bibliotecas de clientes podem não ser as versões mais recentes exigidas pelos Serviços de Análise do Azure. O mesmo se aplica a aplicações personalizadas ou a outras interfaces, como sCmd, TOM ou ADOMD.NET. Estas aplicações requerem a instalação manual ou programática das bibliotecas. As bibliotecas de clientes para instalação manual estão incluídas em pacotes de funcionalidades SQL Server como pacotes distribuíveis. No entanto, estas bibliotecas de clientes estão ligadas à versão SQL Server e podem não ser as mais recentes.  

As bibliotecas de clientes para ligações com clientes são diferentes dos fornecedores de dados necessários para se conectarem de um servidor dos Serviços de Análise Azure a uma fonte de dados. Para saber mais sobre as ligações de fonte de dados, consulte [as ligações](analysis-services-datasource.md)de origem de dados .

## <a name="client-library-types"></a>Tipos de biblioteca de clientes

### <a name="analysis-services-ole-db-provider-msolap"></a>Serviços de Análise Provedor oLE DB (MSOLAP) 

 Serviços de Análise OLE DB Provider (MSOLAP) é a biblioteca de clientes nativos para ligações de base de dados de Serviços de Análise. É usado indiretamente tanto pela ADOMD.NET como pela AMO, delegando pedidos de ligação ao fornecedor de dados. Também pode ligar diretamente para o Fornecedor OLE DB a partir do código de aplicação.  
  
 O Provedor de Serviços de Análise OLE DB é instalado automaticamente pela maioria das ferramentas e aplicações do cliente usadas para aceder às bases de dados dos Serviços de Análise. Deve ser instalado em computadores utilizados para aceder aos dados dos Serviços de Análise.  
  
 Os fornecedores de OLE DB são frequentemente especificados nas cordas de ligação. Uma cadeia de ligação de serviços de análise utiliza uma nomenclatura diferente para se referir ao Provedor OLE DB: MSOLAP.\<versão>.dll.

### <a name="amo"></a>AMO  

 A AMO é uma biblioteca de clientes gerida usada para a administração de servidores e definição de dados. É instalado e usado por ferramentas e aplicações de clientes. Por exemplo, o SQL Server Management Studio (SSMS) utiliza amo para se ligar aos Serviços de Análise. Uma ligação utilizando AMO é tipicamente mínima, consistindo de `"data source=\<servername>"`. Depois de estabelecida uma ligação, utiliza a API para trabalhar com coleções de bases de dados e objetos importantes. Tanto o Visual Studio como o SSMS usam amo para se ligarem a uma instância de Serviços de Análise.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET é uma biblioteca de clientes de dados gerida usada para consulta de dados dos Serviços de Análise. É instalado e usado por ferramentas e aplicações de clientes. 
  
 Ao ligar-se a uma base de dados, as propriedades da cadeia de ligação para as três bibliotecas são semelhantes. Quase todas as cordas de ligação que define para ADOMD.NET utilizando [o Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) também funciona para amo e para o Provedor db dos Serviços de Análise OLE DB (MSOLAP). Para saber mais, consulte [Serviços &#40;&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services)de Análise de Propriedades de Conexão .  

  
##  <a name="bkmk_LibUpdate"></a>Como determinar a versão da biblioteca do cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Aceda a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Se tiver mais de uma pasta, escolha o número mais alto.
  
2.  Clique à direita **msolap.dll** > **Propriedades** > **Detalhes**. Se o nome de ficheiro for msolap140.dll, é mais antigo do que a versão mais recente e deve ser atualizado.
    
    ![Detalhes da biblioteca do cliente](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se tiver mais de uma pasta, escolha o número mais alto.
2. Clique à direita **na Microsoft.AnalysisServices** > **Propriedades** > **Detalhes**.  

### <a name="adomd"></a>ADOMD

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se tiver mais de uma pasta, escolha o número mais alto.
2. Clique direito **microsoft.AnalysisServices.AdomdClient** > **Properties** > **Detalhes**.  


## <a name="next-steps"></a>Passos seguintes
[Conecte-se com excel](analysis-services-connect-excel.md)    
[Ligar com o Power BI](analysis-services-connect-pbi.md)
