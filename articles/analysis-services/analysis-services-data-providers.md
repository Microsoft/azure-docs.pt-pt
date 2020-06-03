---
title: Bibliotecas de clientes Azure Analysis Services Microsoft Docs
description: Descreve bibliotecas de clientes necessárias para aplicações e ferramentas do cliente para ligar serviços de análise do Azure
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c02be378febacc4db0b077a3be69339ff9710a0
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300914"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Bibliotecas de clientes para ligação aos Serviços de Análise

As bibliotecas de clientes são necessárias para que as aplicações e ferramentas do cliente se conectem aos servidores dos Serviços de Análise. Aplicações de clientes da Microsoft como Power BI Desktop, Excel, SQL Server Management Studio (SSMS) e Extensão de projetos de serviços de análise para o Visual Studio instalam as três bibliotecas de clientes e atualizam-nas juntamente com atualizações regulares de aplicações. Em alguns casos, poderá ser necessário instalar versões mais recentes das bibliotecas do cliente. As aplicações personalizadas do cliente também requerem a instalação de bibliotecas de clientes.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Descarregue as mais recentes bibliotecas de clientes (Instalador do Windows)  

|Download  |Versão do Produto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.42.26    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.42.26       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   19.2.0.2    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    19.2.0.2     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO e ADOMD (pacotes NuGet)

As bibliotecas de serviços de gestão de serviços de análise (AMO) e ADOMD estão disponíveis como pacotes instalados a partir de [NuGet.org](https://www.nuget.org/). Recomenda-se que emigre para referências NuGet em vez de utilizar o Instalador do Windows. 

|Pacote  | Versão do Produto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    19.2.0.2     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   19.2.0.2      |

Conjuntos de pacotes NuGet Assemblagem Siga a versão semântica: MAJOR. MENOR. PATCH. As referências nuGet carregam a versão esperada mesmo que exista uma versão diferente no GAC (resultante da instalação do MSI). PATCH é incrementado para cada libertação. As versões AMO e ADOMD são mantidas em sincronização.

## <a name="understanding-client-libraries"></a>Compreender bibliotecas de clientes

Os Serviços de Análise utilizam três bibliotecas de clientes, também conhecidas como fornecedores de dados. ADOMD.NET e os objetos de gestão de serviços de análise (AMO) são bibliotecas de clientes geridas. O Fornecedor de Serviços de Análise OLE DB (MSOLAP DLL) é uma biblioteca de clientes nativo. Normalmente, os três são instalados ao mesmo tempo. **Os Serviços de Análise Azure requerem as versões mais recentes das três bibliotecas.** 

Aplicações de clientes da Microsoft como Power BI Desktop e Excel instalam as três bibliotecas de clientes e atualizam-nas quando as novas versões estão disponíveis. Dependendo da versão ou frequência de atualizações, algumas bibliotecas de clientes podem não ser as versões mais recentes exigidas pelos Serviços de Análise Azure. O mesmo se aplica a aplicações personalizadas ou a outras interfaces, como sCmd, TOM ou ADOMD.NET. Estas aplicações requerem a instalação manual ou programática das bibliotecas. As bibliotecas do cliente para instalação manual estão incluídas nos pacotes de funcionalidades SQL Server como pacotes distribuíveis. No entanto, estas bibliotecas de clientes estão ligadas à versão SQL Server e podem não ser as mais recentes.  

As bibliotecas de clientes para ligações ao cliente são diferentes dos fornecedores de dados necessários para se conectarem de um servidor Azure Analysis Services a uma fonte de dados. Para saber mais sobre as ligações de fonte de dados, consulte [as ligações de fonte de dados](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de clientes

### <a name="analysis-services-ole-db-provider-msolap"></a>Prestador de Serviços de Análise OLE DB (MSOLAP) 

 Serviços de Análise OLE DB Provider (MSOLAP) é a biblioteca de clientes nativo para ligações de base de dados de serviços de análise. É usado indiretamente por ADOMD.NET e AMO, delegando pedidos de ligação ao fornecedor de dados. Também pode ligar diretamente para o OLE DB Provider a partir do código de aplicação.  
  
 O Fornecedor de Serviços de Análise OLE DB é instalado automaticamente pela maioria das ferramentas e aplicações de clientes usadas para aceder às bases de dados dos Serviços de Análise. Deve ser instalado em computadores utilizados para aceder aos dados dos Serviços de Análise.  
  
 Os fornecedores de DB OLE são frequentemente especificados em cadeias de ligação. Uma cadeia de ligação dos Serviços de Análise utiliza uma nomenclatura diferente para se referir ao Provedor de DB OLE: MSOLAP. \<version> Dll.

### <a name="amo"></a>AMO  

 AMO é uma biblioteca de clientes gerida usada para administração de servidores e definição de dados. É instalado e usado por ferramentas e aplicações de clientes. Por exemplo, o SQL Server Management Studio (SSMS) utiliza o AMO para se conectar aos Serviços de Análise. Uma ligação utilizando AMO é tipicamente mínima, consistindo de `"data source=\<servername>"` . Após a criação de uma ligação, utiliza a API para trabalhar com coleções de bases de dados e objetos principais. Tanto o Visual Studio como o SSMS utilizam o AMO para se conectarem a uma instância de Serviços de Análise.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET é uma biblioteca de clientes de dados gerida usada para consulta de dados de Serviços de Análise. É instalado e usado por ferramentas e aplicações de clientes. 
  
 Ao ligar-se a uma base de dados, as propriedades das cadeias de ligação para as três bibliotecas são semelhantes. Quase todas as cadeias de ligação que define para ADOMD.NET utilizando [o Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) também funciona para a AMO e o Fornecedor de Serviços de Análise OLE DB (MSOLAP). Para saber mais, consulte [as propriedades de cadeias de ligação &#40;Serviços de Análise&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Como determinar a versão da biblioteca do cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Aceda a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Se tiver mais de uma pasta, escolha o número mais alto.
  
2.  Clique à direita **msolap.dll**  >  **Detalhes de propriedades**  >  **Details**. Se o nome de ficheiro for msolap140.dll, é mais antigo que a versão mais recente e deve ser atualizado.
    
    ![Detalhes da biblioteca do cliente](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Se tiver mais de uma pasta, escolha o número mais alto.
2. Clique com o botão direito **microsoft.AnalysisServices**  >  **Properties**  >  **Details**.  

### <a name="adomd"></a>ADOMD

1. Aceda a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Se tiver mais de uma pasta, escolha o número mais alto.
2. Clique com o botão direito **Microsoft.AnalysisServices.AdomdClient**  >  **Properties**  >  **Details**.  


## <a name="next-steps"></a>Passos seguintes
[Conecte-se com o Excel](analysis-services-connect-excel.md)    
[Ligar com o Power BI](analysis-services-connect-pbi.md)
